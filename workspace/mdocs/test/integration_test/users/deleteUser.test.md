import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
  password,
  mockPassword,
} from '../data/user.js'
import usersService from '../requiredTest/userService.js'
import messages from '../../../src/common/messages.js'


export const userDelete = () => {
  describe('DELETE USER ', () => {

    let userId
    let token
    let userToken
    let adminId
    let admin2Id
    let superAdminId
    let user2Id
    let adminToken

    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin'
        }

        superAdminId = await usersService.create(superAdminInfo)

        delete superAdminInfo.role

        token = await usersService.login(superAdminInfo)

        const user = {
          username: randomUUID(),
          password: mockPassword,
          role: 'user',
        }

        userId = await usersService.create(user)

        const userInfo = {
          username: randomUUID(),
          password: password,
          role: 'user'
        }

        user2Id = await usersService.create(userInfo)

        delete userInfo.role

        userToken = await usersService.login(userInfo)

        const adminInfo = {
          username: randomUUID(),
          password: password,
          role: 'admin'
        }

        adminId = await usersService.create(adminInfo)

        delete adminInfo.role

        adminToken = await usersService.login(adminInfo)

        const admin2Info = {
          username: randomUUID(),
          password: password,
          role: 'admin'
        }

        admin2Id = await usersService.create(admin2Info)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(user2Id)
      await usersService.delete(adminId)
      await usersService.delete(admin2Id)
    })

    it('user has been removed successfully', (done) => {
      try {
        request
          .delete(`/api/users/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: 1,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not delete a user', (done) => {
      try {
        request
          .delete(`/api/users/${userId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('users can not delete themselves', (done) => {
      try {
        request
          .delete(`/api/users/${user2Id}`)
          .set('Authorization', `Bearer ${userToken}`)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can not delete an admin', (done) => {
      try {
        request
          .delete(`/api/users/${admin2Id}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.USER_NOT_FOUND}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can not delete a superAdmin', (done) => {
      try {
        request
          .delete(`/api/users/${superAdminId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.USER_NOT_FOUND}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('userId is not uuid',(done) => {
      try {
        let wrongUserId = 'kjh23j4h2jh34'

        request
          .delete(`/api/users/${wrongUserId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        token = 'rewfsdfsdfsdfsdf'
        request
          .delete(`/api/users/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .delete(`/api/users/${userId}`)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
